---
title: Inspekcja i zarządzanie zgodnością zasad
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób używać zasad wbudowanych dla Azure Machine Learning przy użyciu Azure Policy.
author: jhirono
ms.author: jhirono
ms.date: 09/15/2020
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: d371ea077b479ad2256e782dadd5d037f53004f2
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900132"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Inspekcja i zarządzanie Azure Machine Learning przy użyciu Azure Policy

[Azure Policy](/azure/governance/policy) jest narzędziem do zarządzania, które umożliwia upewnienie się, że zasoby platformy Azure są zgodne z zasadami. Za pomocą Azure Machine Learning można przypisać następujące zasady:

* **Klucz zarządzany przez klienta** : Inspekcja lub wymuszanie, czy obszary robocze muszą używać klucza zarządzanego przez klienta.
* **Link prywatny** : Inspekcja, czy obszary robocze używają prywatnego punktu końcowego do komunikacji z siecią wirtualną.

Zasady można ustawić w różnych zakresach, na przykład na poziomie subskrypcji lub grupy zasobów. Aby uzyskać więcej informacji, zobacz [dokumentację Azure Policy](/azure/governance/policy/overview).

## <a name="built-in-policies"></a>Wbudowane zasady

Azure Machine Learning zawiera zestaw zasad, których można użyć dla typowych scenariuszy z Azure Machine Learning. Te definicje zasad można przypisać do istniejącej subskrypcji lub użyć ich jako podstawy, aby utworzyć własne definicje niestandardowe. Aby zapoznać się z pełną listą wbudowanych zasad dla Azure Machine Learning, zobacz [zasady wbudowane dla Azure Machine Learning](/azure/governance/policy/samples/built-in-policies#machine-learning).

Aby wyświetlić wbudowane definicje zasad powiązane z Azure Machine Learning, wykonaj następujące czynności:

1. Przejdź do __Azure Policy__ w [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję __definicje__ .
1. W __polu Typ__ wybierz pozycję _wbudowane_ , a dla __kategorii__ wybierz pozycję __Machine Learning__ .

W tym miejscu możesz wybrać definicje zasad, aby je wyświetlić. Podczas wyświetlania definicji można użyć linku __Przypisz__ , aby przypisać zasady do określonego zakresu, i skonfigurować parametry zasad. Aby uzyskać więcej informacji, zobacz [przypisywanie zasad — Portal](/azure/governance/policy/assign-policy-portal).

Zasady można także przypisywać przy użyciu [Azure PowerShell](/azure/governance/policy/assign-policy-powershell), [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/governance/policy/assign-policy-azurecli)i [szablonów](/azure/governance/policy/assign-policy-template).

## <a name="workspaces-encryption-with-customer-managed-key"></a>Szyfrowanie obszarów roboczych z kluczem zarządzanym przez klienta

Określa, czy obszary robocze powinny być szyfrowane za pomocą klucza zarządzanego przez klienta (CMK), czy też przy użyciu klucza zarządzanego przez firmę Microsoft do szyfrowania metryk i metadanych. Aby uzyskać więcej informacji na temat korzystania z programu CMK, zobacz sekcję [Azure Cosmos DB](concept-enterprise-security.md#azure-cosmos-db) w artykule dotyczącym zabezpieczeń przedsiębiorstwa.

Aby skonfigurować te zasady, należy ustawić dla parametru efektu wartość __Inspekcja__ lub __odmowa__ . Jeśli ustawiono opcję __Inspekcja__ , można utworzyć obszary robocze bez CMK i w dzienniku aktywności zostanie utworzone zdarzenie ostrzegawcze.

Jeśli zasady są ustawione na __Odmów__ , nie można utworzyć obszaru roboczego, chyba że określi CMK. Próba utworzenia obszaru roboczego bez CMK powoduje wystąpienie błędu podobnego do `Resource 'clustername' was disallowed by policy` i powoduje utworzenie błędu w dzienniku aktywności. Identyfikator zasad jest również zwracany w ramach tego błędu.

## <a name="workspaces-should-use-private-link"></a>Obszary robocze powinny używać linku prywatnego

Określa, czy obszary robocze powinny używać prywatnego linku platformy Azure do komunikowania się z usługą Azure Virtual Network. Aby uzyskać więcej informacji na temat korzystania z linku prywatnego, zobacz [Konfigurowanie prywatnego linku do obszaru roboczego](how-to-configure-private-link.md).

Aby skonfigurować te zasady, ustaw dla parametru efektu wartość __Inspekcja__ . Jeśli utworzysz obszar roboczy bez użycia linku prywatnego, w dzienniku aktywności zostanie utworzone zdarzenie ostrzegawcze.

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja usługi Azure Policy](/azure/governance/policy/overview)
* [Wbudowane zasady dla Azure Machine Learning](policy-reference.md)
* [Praca z zasadami zabezpieczeń przy użyciu Azure Security Center](/azure/security-center/tutorial-security-policy)