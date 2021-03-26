---
title: Kontrolowanie ruchu sieciowego w usłudze Azure HDInsight
description: Poznaj techniki kontrolowania ruchu przychodzącego i wychodzącego do klastrów usługi Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 66b078e8dfa1daa100978f04283e9bba7158bddf
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867036"
---
# <a name="control-network-traffic-in-azure-hdinsight"></a>Kontrolowanie ruchu sieciowego w usłudze Azure HDInsight

Ruch sieciowy w sieciach wirtualnych platformy Azure można kontrolować przy użyciu następujących metod:

* **Sieciowe grupy zabezpieczeń** (sieciowej grupy zabezpieczeń) umożliwiają filtrowanie ruchu przychodzącego i wychodzącego do sieci. Aby uzyskać więcej informacji, zobacz [Filtrowanie ruchu sieciowego przy użyciu grup zabezpieczeń sieci](../virtual-network/network-security-groups-overview.md) .

* **Wirtualne urządzenia sieciowe** (urządzenie WUS) mogą być używane tylko z ruchem wychodzącym. Urządzeń WUS replikację funkcji urządzeń, takich jak zapory i routery. Aby uzyskać więcej informacji, zobacz dokument [urządzenia sieciowe](https://azure.microsoft.com/solutions/network-appliances) .

Usługa HDInsight wymaga nieograniczonego dostępu do kondycji usługi HDInsight i usług zarządzania zarówno dla ruchu przychodzącego, jak i wychodzącego z sieci wirtualnej. W przypadku korzystania z sieciowych grup zabezpieczeń należy upewnić się, że te usługi mogą nadal komunikować się z klastrem HDInsight.

:::image type="content" source="./media/control-network-traffic/hdinsight-vnet-diagram.png" alt-text="Diagram obiektów usługi HDInsight utworzonych w niestandardowej sieci wirtualnej platformy Azure" border="false":::

## <a name="hdinsight-with-network-security-groups"></a>HDInsight z sieciowymi grupami zabezpieczeń

Jeśli planujesz używać **grup zabezpieczeń sieci** do kontrolowania ruchu sieciowego, przed zainstalowaniem usługi HDInsight wykonaj następujące czynności:

1. Określ region platformy Azure, który ma być używany przez usługę HDInsight.

2. Zidentyfikuj Tagi usługi wymagane przez usługę HDInsight dla danego regionu. Istnieją różne sposoby uzyskiwania następujących tagów usługi:
    1. Zapoznaj się z listą opublikowanych tagów usługi w [znacznikach usługi Network Security Group (sieciowej grupy zabezpieczeń) w usłudze Azure HDInsight](hdinsight-service-tags.md). 
    2. Jeśli region nie znajduje się na liście, użyj [interfejsu API odnajdywania tagów usługi](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) , aby znaleźć tag usługi dla regionu.
    3. Jeśli nie możesz użyć interfejsu API, Pobierz [plik JSON znacznika usługi](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) i wyszukaj żądany region.


3. Utwórz lub zmodyfikuj sieciowe grupy zabezpieczeń dla podsieci, w której ma zostać zainstalowana Usługa HDInsight.

    * __Sieciowe grupy zabezpieczeń__: Zezwalaj na ruch __przychodzący__ na porcie __443__ z adresów IP. Dzięki temu usługi HDInsight Management Services mogą dotrzeć do klastra spoza sieci wirtualnej. W przypadku klastrów z włączonym __serwerem proxy REST Kafka__ należy również zezwolić na ruch __przychodzący__ na porcie __9400__ . Zapewni to dostęp do serwera proxy REST Kafka.

Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [Omówienie sieciowych grup zabezpieczeń](../virtual-network/network-security-groups-overview.md).

## <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>Kontrolowanie ruchu wychodzącego z klastrów usługi HDInsight

Aby uzyskać więcej informacji na temat sterowania ruchem wychodzącym z klastrów usługi HDInsight, zobacz [Konfigurowanie ograniczenia ruchu sieciowego wychodzącego dla klastrów Azure HDInsight](hdinsight-restrict-outbound-traffic.md).

### <a name="forced-tunneling-to-on-premises"></a>Wymuszone tunelowanie do lokalnego

Wymuszone tunelowanie jest konfiguracją routingu zdefiniowaną przez użytkownika, w której cały ruch z podsieci jest wymuszany w określonej sieci lub lokalizacji, na przykład w sieci lokalnej lub w zaporze. Wymuszone tunelowanie całego transferu danych z powrotem do lokalnego _nie_ jest zalecane z powodu dużego wolumenu transferu danych i potencjalnego wpływu na wydajność.

Klienci, którzy chcą skonfigurować Wymuszone tunelowanie, powinni używać [niestandardowych magazynów metadanych](./hdinsight-use-external-metadata-stores.md) i skonfigurować odpowiednią łączność z podsieci klastra lub sieci lokalnej do tych niestandardowych magazynów metadanych.

Aby zapoznać się z przykładem konfiguracji UDR za pomocą zapory platformy Azure, zobacz [Konfigurowanie ograniczeń ruchu sieciowego wychodzącego dla klastrów usługi Azure HDInsight](hdinsight-restrict-outbound-traffic.md).

## <a name="required-ports"></a>Wymagane porty

Jeśli planujesz używanie **zapory** i dostęp do klastra z zewnątrz na określonych portach, może być konieczne zezwolenie na ruch na tych portach wymaganych przez ten scenariusz. Domyślnie żadne specjalne filtrowanie portów nie jest konieczne, o ile ruch związany z zarządzaniem platformy Azure opisany w poprzedniej sekcji może dotrzeć do klastra na porcie 443.

Aby uzyskać listę portów dla określonych usług, zobacz [porty używane przez usługi Apache Hadoop Services w usłudze HDInsight](hdinsight-hadoop-port-settings-for-services.md) .

Aby uzyskać więcej informacji na temat reguł zapory dla urządzeń wirtualnych, zobacz dokument [scenariusza wirtualnego urządzenia](../virtual-network/virtual-network-scenario-udr-gw-nva.md) .

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z przykładami kodu i przykłady tworzenia sieci wirtualnych platformy Azure, zobacz [Tworzenie sieci wirtualnych dla klastrów usługi Azure HDInsight](hdinsight-create-virtual-network.md).
* Aby zapoznać się z kompleksowym przykładem konfigurowania usługi HDInsight w celu nawiązania połączenia z siecią lokalną, zobacz [łączenie usługi HDInsight z siecią lokalną](./connect-on-premises-network.md).
* Aby uzyskać więcej informacji na temat sieci wirtualnych platformy Azure, zobacz [Omówienie usługi azure Virtual Network](../virtual-network/virtual-networks-overview.md).
* Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [Network Security Groups](../virtual-network/network-security-groups-overview.md).
* Aby uzyskać więcej informacji na temat tras zdefiniowanych przez użytkownika, zobacz [trasy zdefiniowane przez użytkownika i przekazywanie adresów IP](../virtual-network/virtual-networks-udr-overview.md).
* Aby uzyskać więcej informacji na temat sieci wirtualnych, zobacz [Plan sieci wirtualnych for HDInsight](./hdinsight-plan-virtual-network-deployment.md).