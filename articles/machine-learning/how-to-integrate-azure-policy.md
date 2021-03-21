---
title: Inspekcja i zarządzanie zgodnością zasad
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą Azure Policy używać wbudowanych zasad dla Azure Machine Learning, aby upewnić się, że obszary robocze są zgodne z wymaganiami.
author: aashishb
ms.author: aashishb
ms.date: 03/12/2021
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.openlocfilehash: 21b07130e99ad4fac9a0a9b2d11aca852a1f205f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584316"
---
# <a name="audit-and-manage-azure-machine-learning-using-azure-policy"></a>Inspekcja i zarządzanie Azure Machine Learning przy użyciu Azure Policy

[Azure Policy](../governance/policy/index.yml) jest narzędziem do zarządzania, które umożliwia upewnienie się, że zasoby platformy Azure są zgodne z zasadami. Za pomocą Azure Machine Learning można przypisać następujące zasady:

* **Klucz zarządzany przez klienta**: Inspekcja lub wymuszanie, czy obszary robocze muszą używać klucza zarządzanego przez klienta.
* **Link prywatny**: Inspekcja lub wymuszanie, czy obszary robocze komunikują się z siecią wirtualną za pomocą prywatnego punktu końcowego.
* **Prywatny punkt końcowy**: Skonfiguruj podsieć Virtual Network platformy Azure, w której ma zostać utworzony prywatny punkt końcowy.
* **Strefa prywatna strefa DNS**: Skonfiguruj prywatną strefę DNS do użycia dla prywatnego linku.

Zasady można ustawić w różnych zakresach, na przykład na poziomie subskrypcji lub grupy zasobów. Aby uzyskać więcej informacji, zobacz [dokumentację Azure Policy](../governance/policy/overview.md).

## <a name="built-in-policies"></a>Wbudowane zasady

Azure Machine Learning zawiera zestaw zasad, których można użyć dla typowych scenariuszy z Azure Machine Learning. Te definicje zasad można przypisać do istniejącej subskrypcji lub użyć ich jako podstawy, aby utworzyć własne definicje niestandardowe. Aby zapoznać się z pełną listą wbudowanych zasad dla Azure Machine Learning, zobacz [zasady wbudowane dla Azure Machine Learning](../governance/policy/samples/built-in-policies.md#machine-learning).

Aby wyświetlić wbudowane definicje zasad powiązane z Azure Machine Learning, wykonaj następujące czynności:

1. Przejdź do __Azure Policy__ w [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję __definicje__.
1. W __polu Typ__ wybierz pozycję _wbudowane_, a dla __kategorii__ wybierz pozycję __Machine Learning__.

W tym miejscu możesz wybrać definicje zasad, aby je wyświetlić. Podczas wyświetlania definicji można użyć linku __Przypisz__ , aby przypisać zasady do określonego zakresu, i skonfigurować parametry zasad. Aby uzyskać więcej informacji, zobacz [przypisywanie zasad — Portal](../governance/policy/assign-policy-portal.md).

Zasady można także przypisywać przy użyciu [Azure PowerShell](../governance/policy/assign-policy-powershell.md), [interfejsu wiersza polecenia platformy Azure](../governance/policy/assign-policy-azurecli.md)i [szablonów](../governance/policy/assign-policy-template.md).

## <a name="workspace-encryption-with-customer-managed-key"></a>Szyfrowanie obszaru roboczego z kluczem zarządzanym przez klienta

Określa, czy obszar roboczy powinien być szyfrowany za pomocą klucza zarządzanego przez klienta, czy za pomocą klucza zarządzanego przez firmę Microsoft do szyfrowania metryk i metadanych. Aby uzyskać więcej informacji na temat korzystania z klucza zarządzanego przez klienta, zapoznaj się z sekcją [Azure Cosmos DB](concept-data-encryption.md#azure-cosmos-db) artykułu szyfrowanie danych.

Aby skonfigurować te zasady, należy ustawić dla parametru efektu wartość __Inspekcja__ lub __odmowa__. Jeśli ustawiono opcję __Inspekcja__, można utworzyć obszar roboczy bez klucza zarządzanego przez klienta, a w dzienniku aktywności zostanie utworzone zdarzenie ostrzegawcze.

Jeśli zasady są ustawione na __Odmów__, nie można utworzyć obszaru roboczego, chyba że określi klucz zarządzany przez klienta. Próba utworzenia obszaru roboczego bez klucza zarządzanego przez klienta spowoduje wystąpienie błędu podobnego do `Resource 'clustername' was disallowed by policy` i powoduje utworzenie błędu w dzienniku aktywności. Identyfikator zasad jest również zwracany w ramach tego błędu.

## <a name="workspace-should-use-private-link"></a>Obszar roboczy powinien korzystać z prywatnego linku

Określa, czy obszar roboczy powinien korzystać z prywatnego linku platformy Azure, aby komunikować się z usługą Azure Virtual Network. Aby uzyskać więcej informacji na temat korzystania z linku prywatnego, zobacz [Konfigurowanie prywatnego linku do obszaru roboczego](how-to-configure-private-link.md).

Aby skonfigurować te zasady, należy ustawić dla parametru efektu wartość __Inspekcja__ lub __odmowa__. Jeśli ustawiono opcję __Inspekcja__, można utworzyć obszar roboczy bez użycia linku prywatnego, a w dzienniku aktywności zostanie utworzone zdarzenie ostrzegawcze.

Jeśli zasady są ustawione na __Odmów__, nie można utworzyć obszaru roboczego, chyba że używa linku prywatnego. Próba utworzenia obszaru roboczego bez linku prywatnego spowoduje wystąpienie błędu. Ten błąd jest również rejestrowany w dzienniku aktywności. Identyfikator zasad jest zwracany w ramach tego błędu.

## <a name="workspace-should-use-private-endpoint"></a>Obszar roboczy powinien korzystać z prywatnego punktu końcowego

Konfiguruje obszar roboczy do tworzenia prywatnego punktu końcowego w określonej podsieci Virtual Network platformy Azure.

Aby skonfigurować te zasady, ustaw parametr Effect na __DeployIfNotExists__. Ustaw __privateEndpointSubnetID__ na identyfikator Azure Resource Manager podsieci.
## <a name="workspace-should-use-private-dns-zones"></a>Obszar roboczy powinien używać prywatnych stref DNS

Konfiguruje obszar roboczy do korzystania z prywatnej strefy DNS, zastępując domyślne rozpoznawanie nazw DNS dla prywatnego punktu końcowego.

Aby skonfigurować te zasady, ustaw parametr Effect na __DeployIfNotExists__. Ustaw __privateDnsZoneId__ na identyfikator Azure Resource Manager prywatnej strefy DNS do użycia. 

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja usługi Azure Policy](../governance/policy/overview.md)
* [Wbudowane zasady dla Azure Machine Learning](policy-reference.md)
* [Praca z zasadami zabezpieczeń przy użyciu Azure Security Center](../security-center/tutorial-security-policy.md)