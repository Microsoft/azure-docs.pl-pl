---
title: Rozwiązywanie problemów z usługą Azure Red Hat OpenShift
description: Rozwiązywanie typowych problemów z usługą Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: troubleshooting
ms.date: 05/08/2019
ms.openlocfilehash: 55360ef295ff80b700b059d053203458f9f384db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89469086"
---
# <a name="troubleshooting-for-azure-red-hat-openshift"></a>Rozwiązywanie problemów z usługą Azure Red Hat OpenShift

W tym artykule opisano niektóre typowe problemy występujące podczas tworzenia i zarządzania Microsoft Azure klastrów Red Hat OpenShift.

## <a name="retrying-the-creation-of-a-failed-cluster"></a>Ponawianie próby utworzenia klastra, który uległ awarii

W przypadku tworzenia klastra usługi Azure Red Hat OpenShift przy użyciu `az` polecenia CLI nie powiedzie się, ponawianie próby tworzenia będzie kończyć się niepowodzeniem.
Użyj `az openshift delete` , aby usunąć uszkodzony klaster, a następnie utwórz zupełnie nowy klaster.

## <a name="hidden-azure-red-hat-openshift-cluster-resource-group"></a>Ukryta Grupa zasobów klastra usługi Azure Red Hat OpenShift

Obecnie zasób, `Microsoft.ContainerService/openShiftManagedClusters` który jest automatycznie tworzony za pomocą interfejsu wiersza polecenia platformy Azure ( `az openshift create` polecenie), jest ukryty w Azure Portal. W widoku **grupy zasobów** zaznacz opcję **Pokaż ukryte typy** , aby wyświetlić grupę zasobów.

![Zrzut ekranu przedstawiający pole wyboru typu ukrytego w portalu](./media/aro-portal-hidden-type.png)

## <a name="creating-a-cluster-results-in-error-that-no-registered-resource-provider-found"></a>Tworzenie klastra powoduje błąd, który nie znalazł zarejestrowanego dostawcy zasobów

W przypadku tworzenia klastra Wystąpił błąd, który jest `No registered resource provider found for location '<location>' and API version '2019-04-30' for type 'openShiftManagedClusters'. The supported api-versions are '2018-09-30-preview` częścią wersji zapoznawczej i teraz trzeba [zakupić zarezerwowane wystąpienia maszyn wirtualnych platformy Azure](https://aka.ms/openshift/buy) , aby użyć ogólnie dostępnego produktu. Rezerwacja zmniejsza wydatki dzięki przedpłaceniu za w pełni zarządzane usługi platformy Azure. Zapoznaj się z tematami [*Azure Reservations*](../cost-management-billing/reservations/save-compute-costs-reservations.md) , aby dowiedzieć się więcej o rezerwacjach i sposobach ich oszczędności.

## <a name="next-steps"></a>Następne kroki

- Wypróbuj [centrum pomocy Red Hat OpenShift](https://help.openshift.com/) , aby uzyskać więcej informacji na temat rozwiązywania problemów z OpenShift.

- Znajdź odpowiedzi na [często zadawane pytania dotyczące usługi Azure Red Hat OpenShift](openshift-faq.md).