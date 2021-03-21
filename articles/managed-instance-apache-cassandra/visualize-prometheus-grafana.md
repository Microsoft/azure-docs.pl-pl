---
title: Konfigurowanie Grafana w celu wizualizacji metryk emitowanych z wystąpienia zarządzanego platformy Azure dla oprogramowania Apache Cassandra
description: Dowiedz się, jak zainstalować i skonfigurować Grafana na maszynie wirtualnej w celu wizualizacji metryk emitowanych z wystąpienia zarządzanego platformy Azure dla klastra Apache Cassandra.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: ed0ff343595429a4cb81fef280203f1180eeb098
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101746587"
---
# <a name="configure-grafana-to-visualize-metrics-emitted-from-the-managed-instance-cluster"></a>Konfigurowanie Grafana w celu wizualizacji metryk emitowanych z klastra wystąpienia zarządzanego

> [!IMPORTANT]
> Wystąpienie zarządzane platformy Azure dla systemu Apache Cassandra jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W przypadku wdrażania wystąpienia zarządzanego platformy Azure dla klastra Apache Cassandra usługa inicjuje serwer, który hostuje [Prometheus](https://prometheus.io/) , które mogą być używane przez różne narzędzia klienta. Prometheus to rozwiązanie do monitorowania Open Source. Wystąpienie zarządzane emituje metryki i zachowuje 10 minut lub 10 GB danych (w zależności od progu osiągnie pierwszy). W tym artykule opisano sposób konfigurowania Grafana do wizualizacji metryk emitowanych z klastra wystąpienia zarządzanego. Do wizualizacji metryk są wymagane następujące zadania:

* Wdróż maszynę wirtualną Ubuntu w obrębie Virtual Network platformy Azure, w której znajduje się wystąpienie zarządzane.
* Zainstaluj [Narzędzie Grafana](https://grafana.com/grafana/) Open Source, aby utworzyć pulpity nawigacyjne i wizualizować metryki emitowane z Prometheus.

## <a name="deploy-a-ubuntu-server"></a>Wdrażanie serwera Ubuntu

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Przejdź do grupy zasobów, w której znajduje się klaster wystąpienia zarządzanego. Wybierz pozycję **Dodaj** i Wyszukaj **Ubuntu Server 18,04 LTS** Image:

   :::image type="content" source="./media/visualize-prometheus-grafana/select-ubuntu-image.png" alt-text="Znajdź obraz serwera Ubuntu z Azure Portal." border="true":::

1. Wybierz obraz i wybierz pozycję **Utwórz**.

1. W bloku **Utwórz maszynę wirtualną** wprowadź wartości dla następujących pól, możesz pozostawić wartości domyślne dla innych pól:

   * **Nazwa maszyny wirtualnej** — wprowadź nazwę maszyny wirtualnej.
   * **Region** — wybierz ten sam region, w którym wdrożono Virtual Network.

   :::image type="content" source="./media/visualize-prometheus-grafana/create-vm-ubuntu.png" alt-text="Wypełnij formularz, aby utworzyć maszynę wirtualną przy użyciu obrazu serwera Ubuntu." border="true":::

1. Na karcie **Sieć** wybierz Virtual Network, w którym wdrożono wystąpienie zarządzane:

   :::image type="content" source="./media/visualize-prometheus-grafana/configure-networking-details.png" alt-text="Skonfiguruj ustawienia sieciowe serwera Ubuntu." border="true":::

1. Na koniec wybierz pozycję **Przegląd + Utwórz** , aby utworzyć serwer Grafana.

## <a name="install-grafana"></a>Zainstaluj Grafana

1. W Azure Portal Otwórz Virtual Network, w którym wdrożono wystąpienie zarządzane i serwer Grafana. Powinien zostać wyświetlony wystąpienie zestawu skalowania maszyn wirtualnych o nazwie **Cassandra-skoku (wystąpienie 0)**. Te metryki Prometheus są hostowane w tym zestawie skalowania maszyn wirtualnych. Zanotuj adres IP tego wystąpienia:

   :::image type="content" source="./media/visualize-prometheus-grafana/prometheus-instance-address.png" alt-text="Pobierz adres IP wystąpienia Prometheus." border="true":::

1. Połącz się z nowo utworzonym serwerem Ubuntu przy użyciu [interfejsu wiersza polecenia platformy Azure](../virtual-machines/linux/ssh-from-windows.md#ssh-clients) lub preferowanego narzędzia klienta, aby nawiązać połączenie za pośrednictwem protokołu SSH.

1. Po nawiązaniu połączenia z maszyną wirtualną należy zainstalować i skonfigurować Grafana, aby połączyć się z zestawem skalowania maszyn wirtualnych, w którym są hostowane metryki. Otwórz wiersz polecenia i wprowadź polecenie, `nano` Aby otworzyć Edytor tekstu nano. Wklej następujący skrypt do edytora tekstów, pamiętaj, aby zastąpić `<prometheus IP address>` adres IP zapisany w poprzednim kroku:

   ```bash
   #!/bin/bash
   
   echo "Installing Grafana..."
   
   if ! $SSH dpkg -s grafana prometheus > /dev/null; then
       echo "Installing packages."
       echo 'deb https://packages.grafana.com/oss/deb stable main' | $SSH sudo tee /etc/apt/sources.list.d/grafana.list > /dev/null
       curl https://packages.grafana.com/gpg.key | $SSH sudo apt-key add -
       $SSH sudo apt-get update
       $SSH sudo apt-get install -y grafana prometheus
   else
       echo "Skipping package installation"
   fi
   
   echo "Configuring grafana"
   cat <<EOF | $SSH sudo tee /etc/grafana/provisioning/datasources/prometheus.yml
   apiVersion: 1
   datasources:
     - name: Prometheus
       type: prometheus
       url: https://<prometheus IP address>:9443
       jsonData:
         tlsSkipVerify: true
   EOF
   
   echo "Restarting Grafana"
   $SSH sudo systemctl enable grafana-server
   $SSH sudo systemctl restart grafana-server
   
   echo "Installing Grafana plugins"
   $SSH sudo grafana-cli plugins install natel-discrete-panel
   $SSH sudo grafana-cli plugins install grafana-polystat-panel
   $SSH sudo systemctl restart grafana-server
   ```

1. Wpisz `ctrl + X` , aby zapisać plik. Można nazwać plik `grafana.sh` .

1. Wprowadź `./grafana.sh` polecenie w wierszu polecenia, aby zainstalować Grafana.

1. Po zakończeniu instalacji Grafana będzie dostępna na **porcie 3000** w adresie IP serwera, jak pokazano na poniższym zrzucie ekranu:

   :::image type="content" source="./media/visualize-prometheus-grafana/open-grafana-port.png" alt-text="Uruchom Grafana na porcie 3000." border="true":::

1. Możesz wybrać spośród pulpitów nawigacyjnych typu "open source" utworzonych dla oprogramowania Apache Cassandra w Grafana, takich jak plik JSON z [omówieniem klastra](https://github.com/TheovanKraay/cassandra-exporter/blob/master/grafana/instaclustr/cluster-overview.json) . Pobierz i zaimportuj definicję JSON pulpitu nawigacyjnego do Grafana:

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-import.png" alt-text="Zaimportuj definicję JSON Grafana." border="true":::

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-upload-json.png" alt-text="Przekaż definicję JSON Grafana." border="true":::

1. Następnie można monitorować klaster wystąpienia zarządzanego Cassandra z wybranym pulpitem nawigacyjnym:

   :::image type="content" source="./media/visualize-prometheus-grafana/monitor-cassandra-metrics.gif" alt-text="Wyświetl metryki wystąpienia zarządzanego Cassandra na pulpicie nawigacyjnym." border="true":::

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób konfigurowania pulpitów nawigacyjnych w celu wizualizacji metryk w programie Prometheus przy użyciu Grafana. Dowiedz się więcej o usłudze Azure Managed instance for Apache Cassandra z następującymi artykułami:

* [Omówienie wystąpienia zarządzanego na platformie Azure dla platformy Apache Cassandra](introduction.md)
* [Wdróż zarządzany Klaster Apache Spark z Azure Databricks (wersja zapoznawcza)](deploy-cluster-databricks.md)
