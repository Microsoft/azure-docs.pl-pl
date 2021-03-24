---
title: Nieodświeżone alerty Apache Ambari w usłudze Azure HDInsight
description: Dyskusja i analiza możliwych przyczyn i rozwiązań dotyczących nieodświeżonych alertów Apache Ambari w usłudze HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 22985f295e504a2d73015f1dc96064f89be47bd7
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866934"
---
# <a name="scenario-apache-ambari-stale-alerts-in-azure-hdinsight"></a>Scenariusz: usługa Apache Ambari nieodświeżone alerty w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

W interfejsie użytkownika Apache Ambari może zostać wyświetlony następujący alert:

:::image type="content" source="./media/apache-ambari-troubleshoot-stale-alerts/ambari-stale-alerts-example.png" alt-text="Przykład nieodświeżonego alertu Apache Ambari" border="true":::

## <a name="cause"></a>Przyczyna

Agenci Ambari stale monitorują kondycję wielu zasobów. *Alerty* można skonfigurować w taki sposób, aby powiadamiać użytkownika o tym, czy określone właściwości klastra znajdują się w ramach wstępnie określonych progów. Po każdym uruchomieniu kontroli zasobów, jeśli spełniony jest warunek alertu, agenci Ambari zgłaszają stan z powrotem do serwera Ambari i wyzwalają alert. Jeśli alert nie jest sprawdzany zgodnie z interwałem w jego profilu alertu, serwer wyzwala alert dotyczący *starych alertów serwera Ambari* .

Istnieją różne przyczyny, dla których Sprawdzenie kondycji może nie być uruchamiane w określonym interwale:

* Hosty są mocno używane (wysokie użycie procesora), dzięki czemu Agent Ambari nie może uzyskać wystarczającej ilości zasobów systemowych do uruchamiania alertów na czas.

* Klaster jest zajęty wykonywaniem wielu zadań lub usług w okresie dużego obciążenia.

* Niewielka liczba hostów w klastrze obsługuje wiele składników i dlatego jest wymagana do uruchamiania wielu alertów. Jeśli liczba składników jest duża, zadania alertów mogą pominąć zaplanowane interwały.

## <a name="resolution"></a>Rozwiązanie

Wypróbuj następujące metody, aby rozwiązać problemy z nieodświeżonymi alertami Ambari.

### <a name="increase-the-alert-interval-time"></a>Zwiększ czas trwania okresu alertu

Można zwiększyć wartość pojedynczego interwału alertu na podstawie czasu odpowiedzi i obciążenia klastra:

1. W interfejsie użytkownika Apache Ambari wybierz kartę **alerty** .
1. Wybierz żądaną nazwę definicji alertu.
1. Z definicji wybierz pozycję **Edytuj**.
1. Zwiększ wartość **interwału sprawdzania** , a następnie wybierz pozycję **Zapisz**.

### <a name="increase-the-alert-interval-time-for-ambari-server-alerts"></a>Zwiększ czas interwału alertów dla alertów serwera Ambari

1. W interfejsie użytkownika Apache Ambari wybierz kartę **alerty** .
1. Z listy rozwijanej **grupy** wybierz pozycję **AMBARI default (domyślne**).
1. Wybierz alert dotyczący **alertów serwera Ambari** .
1. Z definicji wybierz pozycję **Edytuj**.
1. Zwiększ wartość **interwału sprawdzania** .
1. Zwiększ wartość **mnożnik interwału** , a następnie wybierz pozycję **Zapisz**.

### <a name="disable-and-reenable-the-alert"></a>Wyłącz i ponownie włącz alert

Aby odrzucić nieodświeżony alert, Wyłącz, a następnie włącz go ponownie:

1. W interfejsie użytkownika Apache Ambari wybierz kartę **alerty** .
1. Wybierz żądaną nazwę definicji alertu.
1. Z definicji wybierz pozycję **włączone** w prawej części interfejsu użytkownika.
1. W oknie podręcznym **potwierdzenia** wybierz pozycję **Potwierdź wyłączenie**.
1. Poczekaj kilka sekund, aż wszystkie wystąpienia alertu na stronie zostały wyczyszczone.
1. Z definicji wybierz pozycję **wyłączone** w prawej części interfejsu użytkownika.
1. W oknie podręcznym **potwierdzenia** wybierz pozycję **Potwierdź włączenie**.

### <a name="increase-the-alert-grace-period"></a>Zwiększ okres prolongaty alertu

Istnieje okres prolongaty, po upływie którego Agent Ambari raportuje, że skonfigurowany alert został pominięty. Jeśli alert minął zaplanowaną godzinę, ale upłynął w okresie prolongaty, nieaktualny alert nie jest generowany.

Wartość domyślna `alert_grace_period` to 5 sekund. To ustawienie można skonfigurować w ambari-agent.ini/etc/Ambari-Agent/conf/. W przypadku hostów, na których występują nieodświeżone alerty w regularnych odstępach czasu, spróbuj zwiększyć wartość do 10. Następnie ponownie uruchom agenta Ambari.

## <a name="next-steps"></a>Następne kroki

Jeśli Twój problem nie został określony w tym miejscu lub nie możesz go rozwiązać, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure w ramach [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się z usługą [@AzureSupport](https://twitter.com/azuresupport) w serwisie Twitter. Jest to oficjalne konto Microsoft Azure na potrzeby ulepszania środowiska klienta. Łączy społeczność platformy Azure z właściwymi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, Prześlij żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Aby to zrobić, wybierz pozycję Pomoc (**?**) w menu portalu lub Otwórz okienko **Pomoc i obsługa techniczna** . Aby uzyskać więcej informacji, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). 

  Obsługa zarządzania subskrypcjami i rozliczeń jest dołączana do subskrypcji Microsoft Azure. Pomoc techniczna jest dostępna w ramach [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).