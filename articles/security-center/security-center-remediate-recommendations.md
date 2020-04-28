---
title: Koryguj zalecenia w Azure Security Center | Microsoft Docs
description: W tym artykule wyjaśniono, jak skorygować zalecenia w Azure Security Center, aby chronić zasoby i zachować zgodność z zasadami zabezpieczeń.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 6863f4bbfc6aabc4e7bf4314ad26dd4590d31362
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77603503"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Rekomendacje dotyczące korygowania w usłudze Azure Security Center

Zalecenia zawierają sugestie dotyczące lepszego zabezpieczania zasobów. Zalecenie jest implementowane przez wykonanie czynności zaradczych, które zostały podane w zaleceniu.

## <a name="remediation-steps"></a>Kroki zaradcze<a name="remediation-steps"></a>

Po przejrzeniu wszystkich zaleceń Zdecyduj, które z nich należy skorygować w pierwszej kolejności. Zalecamy użycie [wpływu na ocenę](security-center-recommendations.md#monitor-recommendations) , aby pomóc w ustaleniu, co należy zrobić w pierwszej kolejności.

1. Na liście kliknij zalecenie.

1. Postępuj zgodnie z instrukcjami w sekcji **kroki zaradcze** . Każde zalecenie ma swój własny zestaw instrukcji. Poniższy zrzut ekranu przedstawia kroki korygowania dotyczące konfigurowania aplikacji tak, aby zezwalały na ruch za pośrednictwem protokołu HTTPS.

    ![Szczegóły rekomendacji](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Po zakończeniu zostanie wyświetlone powiadomienie z informacją o tym, czy korygowanie zakończyło się pomyślnie.

## <a name="quick-fix-remediation"></a>Szybkie rozwiązywanie problemów<a name="one-click"></a>

Szybkie rozwiązanie pozwala szybko skorygować zalecenia dotyczące wielu zasobów. Jest on dostępny tylko dla konkretnych zaleceń. Szybka naprawa upraszcza korygowanie i pozwala szybko zwiększyć swój Bezpieczny wynik, zwiększając bezpieczeństwo środowiska.

Aby zaimplementować szybkie korygowanie poprawek:

1. Z listy zaleceń, które mają **szybką poprawkę!** Kliknij zalecenie.

    [![Wybierz pozycję szybka naprawa.](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. Na karcie **zasoby w złej kondycji** Wybierz zasoby, dla których chcesz wdrożyć zalecenie, a następnie kliknij przycisk **Koryguj**.

    > [!NOTE]
    > Niektóre z wymienionych zasobów mogą być wyłączone, ponieważ nie masz odpowiednich uprawnień, aby je modyfikować.

1. W polu potwierdzenia Przeczytaj szczegóły dotyczące korygowania i implikacje.

    ![Szybka naprawa](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > Implikacje są wymienione w szarym polu okna **korygowanie zasobów** , które jest otwierane po kliknięciu przycisku **Koryguj**. Wyświetlają one zmiany, które są wykonywane podczas korygowania szybkiej korekty.

1. W razie potrzeby Wstaw odpowiednie parametry i zatwierdź korygowanie.

    > [!NOTE]
    > Ukończenie korygowania zasobów na karcie **zasoby w dobrej kondycji** może potrwać kilka minut. Aby wyświetlić akcje korygowania, sprawdź [Dziennik aktywności](#activity-log).

1. Po zakończeniu zostanie wyświetlone powiadomienie z informacją o tym, czy korygowanie zakończyło się pomyślnie.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Szybkie rozwiązywanie problemów z rejestrowaniem w dzienniku aktywności<a name="activity-log"></a>

Operacja korygowania używa wdrożenia szablonu lub wywołania interfejsu API poprawek REST w celu zastosowania konfiguracji w zasobie. Te operacje są rejestrowane w [dzienniku aktywności platformy Azure](../azure-resource-manager/management/view-activity-logs.md).


## <a name="recommendations-with-quick-fix-remediation"></a>Zalecenia z funkcją szybkiego rozwiązywania problemów

|Zalecenie|Domniemanie|
|---|---|
|Inspekcja na serwerach SQL powinna być włączona|Ta akcja spowoduje włączenie inspekcji SQL na tych serwerach i w ich bazach danych. <br>**Uwaga**: <ul><li>Dla każdego regionu wybranych serwerów SQL konto magazynu do zapisywania dzienników inspekcji zostanie utworzone i udostępnione przez wszystkie serwery w tym regionie.</li><li>Aby zapewnić odpowiednią inspekcję, nie należy usuwać ani zmieniać nazw grup zasobów ani kont magazynu.</li></ul>|
|Zaawansowana ochrona danych powinna być włączona w wystąpieniach zarządzanych SQL|Ta akcja spowoduje włączenie zabezpieczeń SQL Advanced Data Security (AD) w wybranych wystąpieniach zarządzanych SQL. <br>**Uwaga**: <ul><li>Dla każdego regionu i grupy zasobów wybranych wystąpień programu SQL konto magazynu do zapisywania wyników skanowania zostanie utworzone i udostępnione przez wszystkie wystąpienia w tym regionie.</li><li> Usługa ADS jest naliczana zgodnie z $15 na wystąpienie zarządzane SQL.</li></ul>|
|Ocena luk w zabezpieczeniach powinna być włączona w wystąpieniach zarządzanych SQL|Ta akcja spowoduje włączenie oceny luk w zabezpieczeniach SQL na wybranych wystąpieniach zarządzanych przez program SQL. <br>**Uwaga**:<ul><li>Ocena luk w zabezpieczeniach SQL jest częścią pakietu SQL Advanced Data Security (AD). Jeśli usługa ADS nie jest już włączona, zostanie ona automatycznie włączona w zarządzanym wystąpieniu.</li><li>Dla każdego regionu i grupy zasobów wybranych wystąpień zarządzanych przez program SQL konto magazynu do przechowywania wyników skanowania zostanie utworzone i udostępnione przez wszystkie wystąpienia w tym regionie.</li><li>Usługa ADS jest naliczana zgodnie z $15 na program SQL Server.</li></ul>||
|Zaawansowana ochrona danych powinna być włączona na serwerach SQL|Ta akcja spowoduje włączenie zaawansowanych zabezpieczeń danych (ADS) na tych wybranych serwerach i ich bazach danych. <br>**Uwaga**:<ul><li>Dla każdego regionu i grupy zasobów wybranych serwerów SQL konto magazynu do przechowywania wyników skanowania zostanie utworzone i udostępnione przez wszystkie serwery w tym regionie. <</li><li>Usługa ADS jest naliczana zgodnie z $15 na program SQL Server.</li></ul>||
|Ocena luk w zabezpieczeniach powinna być włączona na serwerach SQL|Ta akcja spowoduje włączenie oceny luk w zabezpieczeniach programu SQL dla wybranych serwerów i ich baz danych. <br>**Uwaga**:<ul><li>Ocena luk w zabezpieczeniach SQL jest częścią pakietu SQL Advanced Data Security (AD). Jeśli usługa ADS nie jest już włączona, zostanie ona automatycznie włączona na serwerze SQL.</li><li>Dla każdego regionu i grupy zasobów wybranych serwerów SQL konto magazynu do przechowywania wyników skanowania zostanie utworzone i udostępnione przez wszystkie wystąpienia w tym regionie.</li><li>Usługa ADS jest naliczana zgodnie z $15 na program SQL Server.</li></ul>||
|Należy włączyć funkcję przezroczystego szyfrowania danych w bazach danych SQL.|Ta akcja włącza SQL Database Transparent Data Encryption (TDE) dla wybranych baz danych. <br>**Uwaga**: Domyślnie zostaną użyte klucze TDE zarządzane przez usługę.
|Należy włączyć bezpieczny transfer na konta magazynu|Ta akcja aktualizuje zabezpieczenia konta magazynu tak, aby zezwalać na żądania przez bezpieczne połączenia. (HTTPS). <br>**Uwaga**:<ul><li>Wszystkie żądania przy użyciu protokołu HTTP zostaną odrzucone.</li><li>Gdy korzystasz z usługi Azure Files, połączenie bez szyfrowania zakończy się niepowodzeniem, w tym scenariusze korzystające z protokołu SMB 2,1, protokołu SMB 3,0 bez szyfrowania i niektórych typów klienta SMB systemu Linux. Dowiedz się więcej.</li></ul>|
|Aplikacja sieci Web powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Ta akcja spowoduje przekierowanie całego ruchu z protokołu HTTP do HTTPS na wybranych zasobach. <br>**Uwaga**:<ul><li>Punkt końcowy HTTPS, który nie ma certyfikatu SSL, będzie wyświetlany w przeglądarce z powodu błędu "Prywatność". Użytkownicy, którzy posiadają domenę niestandardową, muszą sprawdzić, czy skonfigurowali certyfikat SSL.</li><li>Upewnij się, że pakiet i zapory aplikacji sieci Web chronią usługę App Service, Zezwalaj na przekazywanie sesji HTTPS.</li></ul>|
|aplikacja funkcji powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Ta akcja spowoduje przekierowanie całego ruchu z protokołu HTTP do HTTPS na wybranych zasobach. <br>**Uwaga**:<ul><li>Punkt końcowy HTTPS, który nie ma certyfikatu SSL, będzie wyświetlany w przeglądarce z powodu błędu "Prywatność". Użytkownicy, którzy posiadają domenę niestandardową, muszą sprawdzić, czy skonfigurowali certyfikat SSL.</li><li>Upewnij się, że pakiet i zapory aplikacji sieci Web chronią usługę App Service, Zezwalaj na przekazywanie sesji HTTPS.</li></ul>|
|Aplikacja interfejsu API powinna być dostępna tylko za pośrednictwem protokołu HTTPS|Ta akcja spowoduje przekierowanie całego ruchu z protokołu HTTP do HTTPS na wybranych zasobach. <br>**Uwaga**:<ul><li>Punkt końcowy HTTPS, który nie ma certyfikatu SSL, będzie wyświetlany w przeglądarce z powodu błędu "Prywatność". Użytkownicy, którzy posiadają domenę niestandardową, muszą sprawdzić, czy skonfigurowali certyfikat SSL.</li><li>Upewnij się, że pakiet i zapory aplikacji sieci Web chronią usługę App Service, Zezwalaj na przekazywanie sesji HTTPS.</li></ul>|
|Zdalne debugowanie powinno zostać wyłączone dla aplikacji sieci Web|Ta akcja powoduje wyłączenie debugowania zdalnego.|
|Zdalne debugowanie powinno zostać wyłączone dla aplikacja funkcji|Ta akcja powoduje wyłączenie debugowania zdalnego.|
|Debugowanie zdalne powinno być wyłączone dla aplikacji interfejsu API|Ta akcja powoduje wyłączenie debugowania zdalnego.|
|Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji sieci Web|Ta akcja blokuje dostęp do aplikacji sieci Web innym domenom. Aby zezwolić na określone domeny, wprowadź je w polu dozwolone źródła (oddzielone przecinkami). <br>**Uwaga**: pozostawienie pustego pola spowoduje zablokowanie wszystkich wywołań między źródłami. "Nazwa pola PARAM:" dozwolone źródła "|
|Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacja funkcji|Ta akcja blokuje dostęp do aplikacji funkcji przez inne domeny. Aby zezwolić na określone domeny, wprowadź je w polu dozwolone źródła (oddzielone przecinkami). <br>**Uwaga**: pozostawienie pustego pola spowoduje zablokowanie wszystkich wywołań między źródłami. "Nazwa pola PARAM:" dozwolone źródła "|
|Mechanizm CORS nie powinien zezwalać wszystkim zasobom na dostęp do aplikacji interfejsu API|Ta akcja uniemożliwia innym domenom uzyskanie dostępu do aplikacji interfejsu API. Aby zezwolić na określone domeny, wprowadź je w polu dozwolone źródła (oddzielone przecinkami). <br>**Uwaga**: pozostawienie pustego pola spowoduje zablokowanie wszystkich wywołań między źródłami. "Nazwa pola PARAM:" dozwolone źródła "|
|Agent monitorowania powinien być włączony na maszynach wirtualnych|Ta akcja powoduje zainstalowanie agenta monitorowania na wybranych maszynach wirtualnych. Wybierz obszar roboczy, do którego ma zostać zaraportowany Agent.<ul><li>Jeśli zasady aktualizacji są ustawione na automatyczne, zostaną wdrożone w nowych istniejących wystąpieniach.</li><li>Jeśli zasady aktualizacji są ustawione na ręczne i chcesz zainstalować agenta w istniejących wystąpieniach, zaznacz pole wyboru. [Dowiedz się więcej](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-add-an-extension-to-all-vms-in-my-virtual-machine-scale-set)</li></ul>|
|Dzienniki diagnostyczne w Key Vault powinny być włączone|Ta akcja włącza dzienniki diagnostyczne w magazynach kluczy. Dzienniki diagnostyczne i metryki są zapisywane w wybranym obszarze roboczym.|
|Należy włączyć dzienniki diagnostyczne w usłudze Service Bus|Ta akcja powoduje włączenie dzienników diagnostycznych w usłudze Service Bus. Dzienniki diagnostyczne i metryki są zapisywane w wybranym obszarze roboczym.|

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób korygowania zaleceń w Security Center. Aby dowiedzieć się więcej na temat Security Center, zobacz następujące tematy:

* [Ustawianie zasad zabezpieczeń w Azure Security Center](tutorial-security-policy.md) — informacje na temat konfigurowania zasad zabezpieczeń dla subskrypcji i grup zasobów platformy Azure.
* [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
