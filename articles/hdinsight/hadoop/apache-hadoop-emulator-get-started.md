---
title: Dowiedz się, jak korzystać z piaskownicy Apache Hadoop, emulatora — Azure HDInsight
description: 'Aby rozpocząć uczenie się dotyczące korzystania z ekosystemu Apache Hadoop, można skonfigurować piaskownicę usługi Hadoop z Hortonworks na maszynie wirtualnej platformy Azure. '
keywords: Emulator Hadoop, Piaskownica w usłudze Hadoop
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: how-to
ms.date: 05/29/2019
ms.openlocfilehash: 60f4e4998e1077ca373f47a0cdf35c9d7a064605
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866679"
---
# <a name="get-started-with-an-apache-hadoop-sandbox-an-emulator-on-a-virtual-machine"></a>Wprowadzenie do Apache Hadoop piaskownicy, emulatora na maszynie wirtualnej

Dowiedz się, jak zainstalować Apache Hadoop piaskownicy z Hortonworks na maszynie wirtualnej, aby dowiedzieć się więcej o ekosystemie usługi Hadoop. Piaskownica udostępnia lokalne środowisko programistyczne, aby uzyskać informacje na temat usługi Hadoop, usługi Hadoop rozproszony system plików (HDFS) i przesłania zadania. Po zapoznaniu się z usługą Hadoop możesz zacząć korzystać z platformy Hadoop na platformie Azure, tworząc klaster usługi HDInsight. Aby uzyskać więcej informacji na temat rozpoczynania pracy, zobacz [Rozpoczynanie pracy z usługą Hadoop w usłudze HDInsight](apache-hadoop-linux-tutorial-get-started.md).

## <a name="prerequisites"></a>Wymagania wstępne

* [Oracle VirtualBox](https://www.virtualbox.org/). Pobierz i zainstaluj go w [tym miejscu](https://www.virtualbox.org/wiki/Downloads).

## <a name="download-and-install-the-virtual-machine"></a>Pobierz i zainstaluj maszynę wirtualną

1. Przejdź do [Cloudera do pobrania](https://www.cloudera.com/downloads/hortonworks-sandbox/hdp.html).

1. Kliknij pozycję **VIRTUALBOX** w obszarze **Wybierz typ instalacji** , aby pobrać najnowszą PIASKOWNICĘ Hortonworks na maszynie wirtualnej. Zaloguj się lub Wypełnij formularz zainteresowania produktu.

1. Kliknij przycisk **piaskownicy HDP (Najnowsza)** , aby rozpocząć pobieranie.

Aby uzyskać instrukcje dotyczące konfigurowania piaskownicy, zobacz temat [wdrażanie piaskownicy i Przewodnik instalacji](https://hortonworks.com/tutorial/sandbox-deployment-and-install-guide/section/1/).

Aby pobrać starszą piaskownicę wersji HDP, zobacz linki w obszarze **starsze wersje**.

## <a name="start-the-virtual-machine"></a>Uruchom maszynę wirtualną

1. Otwórz maszynę wirtualną Oracle VirtualBox.
1. W menu **plik** kliknij polecenie **Importuj urządzenie**, a następnie określ obraz piaskownicy Hortonworks.
1. Wybierz piaskownicę Hortonworks, kliknij przycisk **Start**, a następnie pozycję **rozpoczęcie normalne**. Po zakończeniu procesu rozruchu maszyna wirtualna wyświetli instrukcje logowania.

    :::image type="content" source="./media/apache-hadoop-emulator-get-started/virtualbox-normal-start.png" alt-text="normalne uruchomienie programu VirtualBox Manager" border="true":::

1. Otwórz przeglądarkę internetową i przejdź do wyświetlanego adresu URL (zazwyczaj `http://127.0.0.1:8888` ).

## <a name="set-sandbox-passwords"></a>Ustaw hasła piaskownicy

1. **W kroku wprowadzenie** na stronie piaskownicy Hortonworks wybierz pozycję **Wyświetl opcje zaawansowane**. Skorzystaj z informacji na tej stronie, aby zalogować się do piaskownicy przy użyciu protokołu SSH. Użyj podanej nazwy i hasła.

   > [!NOTE]
   > Jeśli nie zainstalowano klienta SSH, można użyć protokołu SSH opartego na sieci Web, który jest udostępniony przez maszynę wirtualną pod adresem **http://localhost:4200/** .

    Przy pierwszym połączeniu przy użyciu protokołu SSH zostanie wyświetlony monit o zmianę hasła dla konta głównego. Wprowadź nowe hasło, które będzie używane podczas logowania przy użyciu protokołu SSH.

2. Po zalogowaniu wprowadź następujące polecenie:

    ```bash
    ambari-admin-password-reset
    ```

    Po wyświetleniu monitu podaj hasło do konta administratora Ambari. Ta wartość jest używana podczas uzyskiwania dostępu do interfejsu użytkownika sieci Web Ambari.

## <a name="use-hive-commands"></a>Korzystanie z poleceń Hive

1. Z poziomu połączenia SSH z piaskownicą Użyj następującego polecenia, aby uruchomić powłokę programu Hive:

    ```bash
    hive
    ```

2. Po rozpoczęciu powłoki Użyj następujących danych, aby wyświetlić tabele dostarczone z piaskownicą:

    ```hiveql
    show tables;
    ```

3. Aby pobrać 10 wierszy z tabeli, użyj następujących danych `sample_07` :

    ```hiveql
    select * from sample_07 limit 10;
    ```

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak używać programu Visual Studio z piaskownicą Hortonworks](./apache-hadoop-visual-studio-tools-get-started.md)

* [Uczenie lin piaskownicy Hortonworks](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)

* [Samouczek usługi Hadoop — wprowadzenie do usługi HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)