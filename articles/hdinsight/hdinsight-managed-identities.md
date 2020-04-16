---
title: Tożsamości zarządzane w usłudze Azure HDInsight
description: Zawiera omówienie implementacji tożsamości zarządzanych w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 1081865a2e138af38ba171197719f08dedf6ffdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408937"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Tożsamości zarządzane w usłudze Azure HDInsight

Tożsamość zarządzana to tożsamość zarejestrowana w usłudze Azure Active Directory (Azure AD), której poświadczenia są zarządzane przez platformę Azure. Z tożsamościami zarządzanymi nie trzeba rejestrować podmiotów usługi w usłudze Azure AD. Lub zachowaj poświadczenia, takie jak certyfikaty.

Tożsamości zarządzane są używane w usłudze Azure HDInsight, aby uzyskać dostęp do usług domeny usługi Azure AD lub uzyskać dostęp do plików w usłudze Azure Data Lake Storage Gen2 w razie potrzeby.

Istnieją dwa typy tożsamości zarządzanych: przypisane przez użytkownika i przypisane do systemu. Usługa Azure HDInsight obsługuje tylko tożsamości zarządzane przypisane przez użytkownika. Usługa HDInsight nie obsługuje tożsamości zarządzanych przypisanych przez system. Tożsamość zarządzana przypisana przez użytkownika jest tworzona jako autonomiczny zasób platformy Azure, który można następnie przypisać do jednego lub kilku wystąpień usługi platformy Azure. Z drugiej strony tożsamości zarządzanej przypisanej do systemu jest tworzony w usłudze Azure AD, a następnie automatycznie włącza się bezpośrednio w określonym wystąpieniu usługi platformy Azure. Żywotność tej tożsamości zarządzanej przypisanej przez system jest następnie powiązana z okresem życia wystąpienia usługi, na które jest włączona.

## <a name="hdinsight-managed-identity-implementation"></a>Implementacja tożsamości zarządzanej usługi HDInsight

W usłudze Azure HDInsight tożsamości zarządzane są aprowizować w każdym węźle klastra. Te składniki tożsamości, jednak są dostępne tylko przez usługę HDInsight. Obecnie nie ma żadnej obsługiwanej metody generowania tokenów dostępu przy użyciu zarządzanych tożsamości zainstalowanych w węzłach klastra USŁUGI HDInsight. W przypadku niektórych usług platformy Azure tożsamości zarządzane są implementowane z punktem końcowym, którego można użyć do uzyskania tokenów dostępu. Użyj tokenów do interakcji z innymi usługami platformy Azure na własną rękę.

## <a name="create-a-managed-identity"></a>Tworzenie tożsamości zarządzanej

Tożsamości zarządzane można tworzyć za pomocą dowolnej z następujących metod:

* [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Interfejs wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

Pozostałe kroki konfigurowania tożsamości zarządzanej zależą od scenariusza, w którym będzie używana.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Scenariusze tożsamości zarządzanej w usłudze Azure HDInsight

Tożsamości zarządzane są używane w usłudze Azure HDInsight w wielu scenariuszach. Szczegółowe instrukcje konfiguracji i konfiguracji można znaleźć w powiązanych dokumentach:

* [Usługa Azure Data Lake Storage 2. generacji](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Pakiet Enterprise Security](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Szyfrowanie dysków za pomocą klucza zarządzanego przez klienta](disk-encryption.md)

## <a name="faq"></a>Często zadawane pytania

### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>Co się stanie, jeśli usunę tożsamość zarządzana po utworzeniu klastra?

Klaster będzie działać na problemy, gdy wymagana jest tożsamość zarządzana. Obecnie nie ma możliwości aktualizacji lub zmiany tożsamości zarządzanej po utworzeniu klastra. Dlatego naszym zaleceniem jest, aby upewnić się, że tożsamość zarządzana nie jest usuwany podczas środowiska wykonawczego klastra. Można też ponownie utworzyć klaster i przypisać nową tożsamość zarządzaną.

## <a name="next-steps"></a>Następne kroki

* [Czym są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)
