---
title: Zarządzane tożsamości w usłudze Azure HDInsight
description: Zawiera omówienie implementacji zarządzanych tożsamości w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 1081865a2e138af38ba171197719f08dedf6ffdb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81408937"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Zarządzane tożsamości w usłudze Azure HDInsight

Tożsamość zarządzana to tożsamość zarejestrowana w Azure Active Directory (Azure AD), której poświadczenia są zarządzane przez platformę Azure. Przy użyciu tożsamości zarządzanych nie trzeba rejestrować nazw głównych usług w usłudze Azure AD. Lub Zachowaj poświadczenia, takie jak certyfikaty.

Tożsamości zarządzane są używane w usłudze Azure HDInsight w celu uzyskiwania dostępu do usług domenowych Azure AD lub dostępu do plików w Azure Data Lake Storage Gen2 w razie potrzeby.

Istnieją dwa typy tożsamości zarządzanych: przypisane przez użytkownika i przypisane do systemu. Usługa Azure HDInsight obsługuje tylko tożsamości zarządzane przypisane przez użytkownika. Usługa HDInsight nie obsługuje zarządzanych tożsamości przypisanych do systemu. Tożsamość zarządzana przypisana przez użytkownika jest tworzona jako autonomiczny zasób platformy Azure, który można następnie przypisać do co najmniej jednego wystąpienia usługi platformy Azure. W przeciwieństwie do usługi Azure AD jest tworzona tożsamość zarządzana przypisana przez system, a następnie automatycznie włączana bezpośrednio w konkretnej usłudze platformy Azure. Okres istnienia tożsamości zarządzanej przypisanej przez system jest następnie związany z cyklem życia wystąpienia usługi, na którym jest włączona.

## <a name="hdinsight-managed-identity-implementation"></a>Implementacja tożsamości zarządzanej usługi HDInsight

W usłudze Azure HDInsight zarządzane tożsamości są obsługiwane w każdym węźle klastra. Te składniki tożsamości są jednak używane tylko przez usługę HDInsight. Obecnie nie jest obsługiwana metoda generowania tokenów dostępu przy użyciu zarządzanych tożsamości zainstalowanych w węzłach klastra usługi HDInsight. W przypadku niektórych usług platformy Azure tożsamości zarządzane są implementowane za pomocą punktu końcowego, którego można użyć do uzyskania tokenów dostępu. Używaj tokenów do współpracy z innymi usługami platformy Azure.

## <a name="create-a-managed-identity"></a>Tworzenie tożsamości zarządzanej

Tożsamości zarządzane można tworzyć przy użyciu dowolnej z następujących metod:

* [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Interfejs wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

Pozostałe kroki konfigurowania tożsamości zarządzanej zależą od scenariusza, w którym będą używane.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Scenariusze zarządzanej tożsamości w usłudze Azure HDInsight

Tożsamości zarządzane są używane w usłudze Azure HDInsight w wielu scenariuszach. Szczegółowe instrukcje dotyczące instalacji i konfiguracji można znaleźć w odpowiednich dokumentach:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Pakiet Enterprise Security](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Szyfrowanie dysków za pomocą klucza zarządzanego przez klienta](disk-encryption.md)

## <a name="faq"></a>Często zadawane pytania

### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>Co się stanie, jeśli usuniesz tożsamość zarządzaną po utworzeniu klastra?

Gdy wymagana jest tożsamość zarządzana, klaster będzie uruchamiał problemy. Obecnie nie ma możliwości zaktualizowania lub zmiany tożsamości zarządzanej po utworzeniu klastra. Zalecamy, aby upewnić się, że zarządzana tożsamość nie jest usuwana podczas środowiska uruchomieniowego klastra. Można też utworzyć ponownie klaster i przypisać nową tożsamość zarządzaną.

## <a name="next-steps"></a>Następne kroki

* [Czym są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)
