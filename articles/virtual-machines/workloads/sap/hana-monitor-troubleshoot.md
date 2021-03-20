---
title: Monitorowanie i rozwiązywanie problemów ze strony platformy HANA na SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Monitorowanie i rozwiązywanie problemów ze strony platformy HANA na SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 83743a6985bef8ce6c03e01ed8d10aa740852106
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668818"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>Monitorowanie i rozwiązywanie problemów ze strony oprogramowania HANA

Aby efektywnie analizować problemy związane z SAP HANA na platformie Azure (duże wystąpienia), warto zawęzić główną przyczynę problemu. Oprogramowanie SAP opublikowało dużą ilość dokumentacji, aby pomóc.

Odpowiednie często zadawane pytania dotyczące wydajności SAP HANA można znaleźć w następujących informacjach o oprogramowaniu SAP:

- [Uwaga dotycząca oprogramowania SAP #2222200 — często zadawane pytania: SAP HANA Network](https://launchpad.support.sap.com/#/notes/2222200)
- [Uwaga dotycząca oprogramowania SAP #2100040 — często zadawane pytania: SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [Uwaga dotycząca oprogramowania SAP #199997 — często zadawane pytania: SAP HANA pamięci](https://launchpad.support.sap.com/#/notes/2177064)
- [Uwaga dotycząca oprogramowania SAP #200000 — często zadawane pytania: SAP HANA Optymalizacja wydajności](https://launchpad.support.sap.com/#/notes/2000000)
- [Uwaga dotycząca oprogramowania SAP #199930 — często zadawane pytania: Analiza SAP HANA we/wy](https://launchpad.support.sap.com/#/notes/1999930)
- [Uwaga dotycząca oprogramowania SAP #2177064 — często zadawane pytania: SAP HANA ponowne uruchomienie usługi i awarie](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>Alerty SAP HANA

Najpierw zapoznaj się z bieżącymi dziennikami alertów SAP HANA. W programie SAP HANA Studio przejdź do **konsoli administracyjnej: alerty: Pokaż: wszystkie alerty**. Na tej karcie zostaną wyświetlone wszystkie SAP HANA alerty dla określonych wartości (wolna pamięć fizyczna, użycie procesora CPU itd.), które wykraczają poza minimalny i maksymalny próg. Domyślnie testy są odświeżane codziennie co 15 minut.

![W programie SAP HANA Studio przejdź do konsoli administracyjnej: alerty: Pokaż: wszystkie alerty](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>Procesor CPU

W przypadku alertu wyzwalanego z powodu nieprawidłowego ustawienia progu rozdzielczość jest resetowana do wartości domyślnej lub bardziej rozsądnej wartości progowej.

![Zresetuj do wartości domyślnej lub bardziej rozsądnej wartości progowej](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Następujące alerty mogą wskazywać na problemy z zasobami procesora:

- Użycie procesora CPU hosta (alert 5)
- Najnowsza operacja punktu zapisu (alert 28)
- Czas trwania punktu zapisu (alert 54)

Możesz zauważyć wysokie użycie procesora CPU w bazie danych SAP HANA z jednego z następujących:

- Alert 5 (użycie procesora CPU hosta) jest zgłaszany dla bieżącego lub wcześniejszego użycia procesora
- Wyświetlane użycie procesora CPU na ekranie przegląd

![Wyświetlane użycie procesora na ekranie przeglądu](./media/troubleshooting-monitoring/image3-cpu-usage.png)

Wykres obciążenia może pokazywać duże użycie procesora CPU lub wysokie użycie w przeszłości:

![Wykres obciążenia może pokazywać duże użycie procesora CPU lub wysokie użycie w przeszłości](./media/troubleshooting-monitoring/image4-load-graph.png)

Alert wyzwolony z powodu wysokiego użycia procesora CPU może być spowodowany przez kilka przyczyn, w tym między innymi: wykonywanie określonych transakcji, ładowanie danych, zadania, które nie odpowiadają, długotrwałe instrukcje SQL i nieprzerwana wydajność zapytań (na przykład w przypadku modułów w systemie HANA).

Szczegółowe instrukcje dotyczące rozwiązywania problemów można znaleźć w witrynie [SAP HANA Rozwiązywanie problemów: przyczyny i rozwiązania dotyczące procesora CPU](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4f/bc915462db406aa2fe92b708b95189/content.htm?frameset=/en/db/6ca50424714af8b370960c04ce667b/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=46&amp;show_children=false) .

## <a name="operating-system"></a>System operacyjny

Jednym z najważniejszych testów dla SAP HANA w systemie Linux jest upewnienie się, że przezroczyste ogromne strony są wyłączone, zobacz [uwagi dotyczące oprogramowania SAP #2131662 – transparenty ogromne strony (THP) na serwerach SAP HANA](https://launchpad.support.sap.com/#/notes/2131662).

- Możesz sprawdzić, czy w następujących poleceniach systemu Linux włączono przezroczyste ogromne strony: **Cat/sys/kernel/mm/transparent \_ hugepage/Enabled**
- Jeśli jest _zawsze_ ujęty w nawiasy klamrowe, oznacza to, że przezroczyste ogromne strony są włączone: [zawsze] madvise nigdy; Jeśli _nigdy nie_ jest ujęty w nawiasy klamrowe, oznacza to, że przezroczyste ogromne strony są wyłączone: zawsze madvise [nigdy]

Następujące polecenie systemu Linux nie zwraca niczego: **RPM-pytań i odpowiedzi grep ulimit.** Jeśli zostanie wyświetlona, _ulimit_ jest zainstalowana, odinstaluj ją natychmiast.

## <a name="memory"></a>Pamięć

Możesz zauważyć, że ilość pamięci przydzielonej przez bazę danych SAP HANA jest większa niż oczekiwano. Następujące alerty wskazują na problemy z wysoką ilością pamięci:

- Użycie pamięci fizycznej hosta (alert 1)
- Użycie pamięci serwera nazw (alert 12)
- Całkowite użycie pamięci w tabelach magazynu kolumn (alert 40)
- Użycie pamięci przez usługi (alert 43)
- Użycie pamięci przez główny magazyn tabel magazynu kolumn (alert 45)
- Pliki zrzutu środowiska uruchomieniowego (alert 46)

Szczegółowe kroki rozwiązywania problemów można znaleźć w witrynie [SAP HANA Rozwiązywanie problemów z pamięcią](https://help.sap.com/saphelp_hanaplatform/helpdata/en/db/6ca50424714af8b370960c04ce667b/content.htm?frameset=/en/59/5eaa513dde43758b51378ab3315ebb/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=26&amp;show_children=false) .

## <a name="network"></a>Sieć

Zapoznaj się z uwagą do programu [sap #2081065 — Rozwiązywanie problemów z siecią SAP HANA](https://launchpad.support.sap.com/#/notes/2081065) i wykonywanie kroków rozwiązywania problemów z siecią w tej notatce SAP.

1. Analizowanie czasu rundy między serwerem a klientem.
  A. Uruchom [_\_ \_ klientów sieciowych_](https://launchpad.support.sap.com/#/notes/1969700)programu SQL Script Hana _._
  
2. Analizowanie komunikacji między węzłami.
  A. Uruchom [_\_ \_ usługę sieciową Hana_](https://launchpad.support.sap.com/#/notes/1969700)skryptu SQL _._

3. Uruchom polecenie systemu Linux **ifconfig** (dane wyjściowe wskazuje, czy występują jakiekolwiek straty pakietu).
4. Uruchom polecenie systemu Linux **tcpdump**.

Ponadto należy użyć narzędzia [IPERF](https://iperf.fr/) Open Source (lub podobnego) do mierzenia wydajności sieci aplikacji.

Szczegółowe kroki rozwiązywania problemów można znaleźć w witrynie [SAP HANA Rozwiązywanie problemów dotyczących wydajności sieci i problemów z łącznością](https://help.sap.com/saphelp_hanaplatform/helpdata/en/a3/ccdff1aedc4720acb24ed8826938b6/content.htm?frameset=/en/dc/6ff98fa36541e997e4c719a632cbd8/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=142&amp;show_children=false) .

## <a name="storage"></a>Storage

Z punktu widzenia użytkownika końcowego aplikacja (lub system jako całość) działa bardzo szybko, nie odpowiada, lub nawet może przestać odpowiadać w przypadku problemów z wydajnością we/wy. Na karcie **woluminy** w programie SAP HANA Studio można zobaczyć dołączone woluminy i woluminy używane przez poszczególne usługi.

![Na karcie Woluminy w programie SAP HANA Studio można zobaczyć dołączone woluminy i woluminy używane przez poszczególne usługi.](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Przyłączone woluminy w dolnej części ekranu można zobaczyć szczegóły woluminów, takie jak pliki i statystyki we/wy.

![Przyłączone woluminy w dolnej części ekranu można zobaczyć szczegóły woluminów, takie jak pliki i statystyki we/wy](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Zapoznaj się z [SAP HANA rozwiązywania problemów: główne przyczyny i rozwiązania związane z usługą we/wy](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/6ff98fa36541e997e4c719a632cbd8/content.htm?frameset=/en/47/4cb08a715c42fe9f7cc5efdc599959/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=55&amp;show_children=false) oraz [SAP HANA rozwiązywania problemów: dysk główny przyczyny i rozwiązania](https://help.sap.com/saphelp_hanaplatform/helpdata/en/47/4cb08a715c42fe9f7cc5efdc599959/content.htm?frameset=/en/44/3e1db4f73d42da859008df4f69e37a/frameset.htm&amp;current_toc=/en/85/d132c3f05e40a2b20c25aa5fd6331b/plain.htm&amp;node_id=53&amp;show_children=false) dotyczące rozwiązywania problemów.

## <a name="diagnostic-tools"></a>narzędzia diagnostyczne

Wykonaj SAP HANA Sprawdzanie kondycji przy użyciu \_ konfiguracji Hana \_ Minichecks. To narzędzie zwraca potencjalnie krytyczne problemy techniczne, które zostały już zgłoszone jako alerty w programie SAP HANA Studio.

Zapoznaj się z [instrukcją SAP uwagi #1969700 — kolekcją instrukcji SQL dla SAP HANA](https://launchpad.support.sap.com/#/notes/1969700) i pobierz plik Statements.zip SQL dołączony do tej notatki. Zapisz ten plik zip na lokalnym dysku twardym.

W SAP HANA Studio na karcie **Informacje o systemie** kliknij prawym przyciskiem myszy w kolumnie **Nazwa** i wybierz polecenie **Importuj instrukcje SQL**.

![W programie SAP HANA Studio na karcie Informacje o systemie kliknij prawym przyciskiem myszy w kolumnie Nazwa i wybierz polecenie Importuj instrukcje SQL](./media/troubleshooting-monitoring/image7-import-statements-a.png)

Wybierz plik Statements.zip SQL przechowywany lokalnie, a folder z odpowiednimi instrukcjami SQL zostanie zaimportowany. W tym momencie wiele różnych testów diagnostycznych można uruchomić za pomocą tych instrukcji SQL.

Na przykład, aby przetestować wymagania dotyczące przepustowości replikacji systemu SAP HANA, kliknij prawym przyciskiem myszy instrukcję **przepustowości** w obszarze **replikacja: przepustowość** i wybierz polecenie **Otwórz** w konsoli SQL.

Zostanie otwarta kompletna instrukcja SQL zezwalająca na zmianę parametrów wejściowych (modyfikacja sekcji), a następnie wykonanie.

![Zostanie otwarta kompletna instrukcja SQL zezwalająca na zmianę parametrów wejściowych (modyfikacja sekcji), a następnie wykonanie](./media/troubleshooting-monitoring/image8-import-statements-b.png)

Innym przykładem jest kliknięcie prawym przyciskiem myszy instrukcji w obszarze **replikacja: przegląd**. Wybierz pozycję **Wykonaj** z menu kontekstowego:

![Innym przykładem jest kliknięcie prawym przyciskiem myszy instrukcji w obszarze replikacja: przegląd. Wybierz pozycję wykonaj z menu kontekstowego](./media/troubleshooting-monitoring/image9-import-statements-c.png)

Powoduje to informacje pomocne w rozwiązywaniu problemów:

![Spowoduje to wyświetlenie informacji, które pomogą w rozwiązywaniu problemów](./media/troubleshooting-monitoring/image10-import-statements-d.png)

Wykonaj te same czynności w przypadku \_ konfiguracji Hana \_ Minichecks i sprawdź, czy w kolumnie _C_ (krytyczne) nie ma żadnych znaków _X_ .

Przykładowe dane wyjściowe:

**Hana \_ Configuration \_ MiniChecks \_ Rev 102.01 + 1** na potrzeby testów ogólnych SAP HANA.

![\_Konfiguracja Hana \_ MiniChecks \_ Rev 102.01 + 1 na potrzeby testów ogólnych SAP HANA](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

**Hana \_ \_Omówienie usług** , w których są obecnie uruchomione usługi SAP HANA.

![\_Usługi Hana \_ — Omówienie dotyczące obecnie uruchomionych usług SAP HANA](./media/troubleshooting-monitoring/image12-services-overview.png)

**Hana \_ \_Statystyka usług** dla SAP HANA informacji o usłudze (procesor CPU, pamięć itp.).

![\_Statystyki usług Hana \_ dla SAP HANA informacji o usłudze](./media/troubleshooting-monitoring/image13-services-statistics.png)

**Hana \_ \_Przegląd konfiguracji \_ Rev110 +** , aby uzyskać ogólne informacje o wystąpieniu SAP HANA.

![\_Omówienie konfiguracji platformy Hana \_ \_ Rev110 +, aby uzyskać ogólne informacje na temat wystąpienia SAP HANA](./media/troubleshooting-monitoring/image14-configuration-overview.png)

**Hana \_ \_Parametry konfiguracji \_ Rev70 +** aby sprawdzić SAP HANA parametrów.

![\_Parametry konfiguracji platformy Hana \_ \_ Rev70 + aby sprawdzić SAP HANA parametry](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

**Następne kroki**

- Zapoznaj się [z wysoką dostępnością skonfigurowaną w SUSE przy użyciu STONITH](ha-setup-with-stonith.md).