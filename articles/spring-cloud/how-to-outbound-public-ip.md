---
title: Jak zidentyfikować wychodzące publiczne adresy IP w chmurze Azure wiosennej
description: Jak wyświetlić statyczne publiczne adresy IP w celu komunikowania się z zasobami zewnętrznymi, takimi jak baza danych, magazyn, Key Vault itd.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 04174b9cffb7e853dee235a4141ccda74a7847c6
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878218"
---
# <a name="how-to-identify-outbound-public-ip-addresses-in-azure-spring-cloud"></a>Jak zidentyfikować wychodzące publiczne adresy IP w chmurze Azure wiosennej

Na tej stronie wyjaśniono, jak wyświetlać statyczne publiczne adresy IP aplikacji w chmurze platformy Azure.  Publiczne adresy IP są używane do komunikacji z zasobami zewnętrznymi, na przykład bazami danych, magazynem i magazynami kluczy.

## <a name="how-ip-addresses-work-in-azure-spring-cloud"></a>Jak działają adresy IP w chmurze sieci platformy Azure

Usługa w chmurze Azure wiosny ma co najmniej jeden wychodzący publiczny adres IP. Liczba wychodzących publicznych adresów IP może się różnić w zależności od warstw i innych czynników. 

Wychodzące publiczne adresy IP są zwykle stałe i pozostają takie same, ale wystąpiły wyjątki.

## <a name="when-outbound-ips-change"></a>Po zmianie wychodzących adresów IP

Każde wystąpienie chmury sieci Azure sprężynowe ma określoną liczbę wychodzących publicznych adresów IP w danym momencie. Każde połączenie wychodzące z aplikacji, takie jak baza danych zaplecza, używa jednego z wychodzących publicznych adresów IP jako źródłowego adresu IP. Adres IP jest wybierany losowo w czasie wykonywania, więc usługa zaplecza musi otworzyć Zaporę do wszystkich wychodzących adresów IP.

Liczba zmian wychodzących publicznych adresów IP podczas wykonywania jednej z następujących czynności:

- Uaktualnij wystąpienie chmury Azure wiosną między warstwami.
- Zgłoś bilet pomocy technicznej, aby uzyskać więcej wychodzących publicznych adresów IP dla potrzeb firmy.

## <a name="find-outbound-ips"></a>Znajdź wychodzące adresy IP

Aby znaleźć publiczne adresy IP, które są obecnie używane przez wystąpienie usługi w Azure Portal, kliknij przycisk **sieci** w okienku nawigacji po lewej stronie tego wystąpienia. Są one wymienione w polu **wychodzące adresy IP** .

Te same informacje można znaleźć, uruchamiając następujące polecenie w Cloud Shell

```Azure CLI
az spring-cloud show --resource-group <group_name> --name <service_name> --query properties.networkProfile.outboundIPs.publicIPs --output tsv
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
* [Dowiedz się więcej o tożsamościach zarządzanych dla zasobów platformy Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Dowiedz się więcej o magazynie kluczy w chmurze Azure wiosny](spring-cloud-tutorial-managed-identities-key-vault.md)
