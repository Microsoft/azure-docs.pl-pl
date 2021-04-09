---
title: Uaktualnianie klastra usługi Azure Red Hat OpenShift
description: Dowiedz się, jak uaktualnić klaster Red Hat OpenShift systemu Azure z systemem OpenShift 4
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 1/10/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: ARO, OpenShift, AZ ARO, Red Hat, CLI
ms.openlocfilehash: 742da12bd3a10cd1f541e9c43f654cfe7df04340
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101720889"
---
# <a name="upgrade-an-azure-red-hat-openshift-aro-cluster"></a>Uaktualnij klaster Red Hat OpenShift (ARO) platformy Azure

Część cyklu życia klastra wyliczeń polega na przeprowadzeniu okresowych uaktualnień do najnowszej wersji OpenShift. Ważne jest zastosowanie najnowszych wersji zabezpieczeń lub uaktualnienie programu w celu uzyskania najnowszych funkcji. W tym artykule opisano sposób uaktualniania wszystkich składników w klastrze OpenShift przy użyciu konsoli sieci Web OpenShift.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.65. Uruchom polecenie `az --version`, aby określić bieżącą wersję. Jeśli konieczne jest zainstalowanie lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

W tym artykule przyjęto założenie, że masz dostęp do istniejącego klastra usługi Azure Red Hat OpenShift jako użytkownik z `admin` uprawnieniami.

## <a name="check-for-available-aro-cluster-upgrades"></a>Sprawdź dostępność aktualizacji klastra ARO

W konsoli sieci Web OpenShift wybierz kolejno pozycje **Administracja**  >  **Ustawienia klastra** i Otwórz kartę **szczegóły** .

Jeśli **stan aktualizacji** klastra odzwierciedla **dostępne aktualizacje**, można zaktualizować klaster.

## <a name="upgrade-your-aro-cluster"></a>Uaktualnianie klastra ARO

Z poziomu konsoli sieci Web w poprzednim kroku Ustaw **kanał** na odpowiedni kanał dla wersji, która ma zostać zaktualizowana, na przykład `stable-4.5` .

Zaznacz wersję, do której chcesz przeprowadzić aktualizację, a następnie wybierz pozycję **Aktualizuj**. Zobaczysz zmianę stanu aktualizacji na: `Update to <product-version> in progress` . Postęp aktualizacji klastra można sprawdzić, obserwując paski postępu dla operatorów i węzłów.

## <a name="next-steps"></a>Następne kroki
- [Dowiedz się, jak uaktualnić klaster ARO przy użyciu interfejsu wiersza polecenia OC](https://docs.openshift.com/container-platform/4.6/updating/updating-cluster-between-minor.html)
- Informacje o dostępnych klasyfikatorach i aktualizacjach platformy kontenerów OpenShift można znaleźć w [sekcji Errata](https://access.redhat.com/downloads/content/290/ver=4.6/rhel---8/4.6.0/x86_64/product-errata) w portalu klienta.
