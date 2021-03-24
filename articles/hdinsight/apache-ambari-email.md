---
title: 'Samouczek: Konfigurowanie powiadomień e-mail Apache Ambari w usłudze Azure HDInsight'
description: W tym artykule opisano sposób korzystania z usługi SendGrid z usługą Apache Ambari na potrzeby powiadomień e-mail.
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/10/2020
ms.openlocfilehash: 5b344c0c4b1db9159d0223c861e5d371cb225f5a
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867206"
---
# <a name="tutorial-configure-apache-ambari-email-notifications-in-azure-hdinsight"></a>Samouczek: Konfigurowanie powiadomień e-mail Apache Ambari w usłudze Azure HDInsight

W tym samouczku skonfigurujesz powiadomienia e-mail Apache Ambari za pomocą usługi SendGrid. Usługa [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) upraszcza zarządzanie klastrem usługi HDInsight i ich monitorowanie, zapewniając łatwy w użyciu interfejs użytkownika sieci Web i interfejs API REST. Usługa Ambari jest dołączana do klastrów usługi HDInsight i służy do monitorowania klastra i wprowadzania zmian w konfiguracji. [SendGrid](https://sendgrid.com/solutions/) to bezpłatna usługa poczty e-mail oparta na chmurze, która zapewnia niezawodne dostarczanie transakcyjnych wiadomości e-mail, skalowalność i analizę w czasie rzeczywistym oraz elastyczne interfejsy API, które ułatwiają integrację niestandardową. W każdym miesiącu klienci platformy Azure mogą odblokować 25 000 bezpłatnych wiadomości e-mail.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Uzyskaj nazwę użytkownika SendGrid
> * Konfigurowanie powiadomień e-mail Apache Ambari

## <a name="prerequisites"></a>Wymagania wstępne

* Konto e-mail SendGrid. Instrukcje można znaleźć w temacie [jak wysyłać pocztą E-mail przy użyciu SendGrid z platformą Azure](../sendgrid-dotnet-how-to-send-email.md) .

* HDInsight An klaster. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu Azure Portal](./hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="obtain-sendgrid-username"></a>Uzyskaj nazwę użytkownika SendGrid

1. W [Azure Portal](https://portal.azure.com)przejdź do zasobu SendGrid.

1. Na stronie Przegląd wybierz pozycję **Zarządzaj**, aby przejść do strony sieci Web SendGrid dla Twojego konta.

    :::image type="content" source="./media/apache-ambari-email/azure-portal-sendgrid-manage.png" alt-text="Omówienie SendGrid w witrynie Azure Portal":::

1. W menu po lewej stronie przejdź do swojej nazwy konta, a następnie **szczegóły konta**.

    :::image type="content" source="./media/apache-ambari-email/sendgrid-dashboard-navigation.png" alt-text="Nawigacja pulpitu nawigacyjnego SendGrid":::

1. Na stronie **szczegóły konta** Zapisz **nazwę użytkownika**.

    :::image type="content" source="./media/apache-ambari-email/sendgrid-account-details.png" alt-text="Szczegóły konta SendGrid":::

## <a name="configure-ambari-e-mail-notification"></a>Konfigurowanie powiadomienia e-mail Ambari

1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net/#/main/alerts` lokalizacji, gdzie `CLUSTERNAME` jest nazwą klastra.

1. Z listy rozwijanej **Akcje** wybierz pozycję **Zarządzaj powiadomieniami**.

1. W oknie **Zarządzanie powiadomieniami o alertach** wybierz **+** ikonę.

    :::image type="content" source="./media/apache-ambari-email/azure-portal-create-notification.png" alt-text="Zrzut ekranu przedstawia okno dialogowe Zarządzanie powiadomieniami o alertach.":::

1. W oknie dialogowym **Tworzenie powiadomienia o alercie** podaj następujące informacje:

    |Właściwość |Opis |
    |---|---|
    |Nazwa|Podaj nazwę powiadomienia.|
    |Grupy|Skonfiguruj je zgodnie z potrzebami.|
    |Ważność|Skonfiguruj je zgodnie z potrzebami.|
    |Opis|Opcjonalny.|
    |Metoda|Pozostaw **wiadomość e-mail**.|
    |Wyślij wiadomość e-mail do|Podaj wiadomości e-mail na potrzeby otrzymywania powiadomień, rozdzielając je przecinkami.|
    |Serwer SMTP|`smtp.sendgrid.net`|
    |Port SMTP|25 lub 587 (dla nieszyfrowanych/TLS połączeń).|
    |Wyślij wiadomość e-mail z|Podaj adres e-mail. Adres nie musi być autentyczny.|
    |Użyj uwierzytelniania|Zaznacz to pole wyboru.|
    |Nazwa użytkownika|Podaj nazwę użytkownika SendGrid.|
    |Hasło|Podaj hasło, które zostało użyte podczas tworzenia zasobu SendGrid na platformie Azure.|
    |Potwierdzenie hasła|Ponownie wprowadź hasło.|
    |Uruchom protokół TLS|Zaznacz to pole wyboru|

    :::image type="content" source="./media/apache-ambari-email/ambari-create-alert-notification.png" alt-text="Zrzut ekranu przedstawia okno dialogowe Tworzenie powiadomienia o alertach.":::

    Wybierz pozycję **Zapisz**. Powrócisz do okna **Zarządzanie powiadomieniami o alertach** .

1. W oknie **Zarządzanie powiadomieniami o alertach** wybierz pozycję **Zamknij**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób konfigurowania powiadomień e-mail Apache Ambari za pomocą usługi SendGrid. Aby dowiedzieć się więcej o platformie Apache Ambari, Skorzystaj z następujących informacji:

* [Zarządzanie klastrami HDInsight przy użyciu internetowego interfejsu użytkownika systemu Apache Ambari](./hdinsight-hadoop-manage-ambari.md)

* [Tworzenie powiadomienia o alercie](https://docs.cloudera.com/HDPDocuments/Ambari-latest/managing-and-monitoring-ambari/content/amb_create_an_alert_notification.html)