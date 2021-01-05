---
title: Rozwiązanie VMware firmy Azure według CloudSimple — eskalacja uprawnień CloudSimple
description: Opisuje sposób eskalacji uprawnień CloudSimple do wykonywania funkcji administracyjnych w programie vCenter w chmurze prywatnej
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d481717a79856583d23f61107678d2ecd1af68d6
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895731"
---
# <a name="escalate-cloudsimple-privileges-to-perform-administrative-functions-in-private-cloud-vcenter"></a>Eskalacja uprawnień CloudSimple do wykonywania funkcji administracyjnych w programie vCenter Private Cloud

Podejście CloudSimple Privileges ma na celu umożliwienie użytkownikom programu vCenter wymaganych uprawnień do wykonywania normalnych operacji. W niektórych przypadkach użytkownik może wymagać dodatkowych uprawnień do wykonania określonego zadania.  Można eskalować uprawnienia użytkownika usługi vCenter SSO przez ograniczony okres.

Przyczyny eskalacji uprawnień mogą być następujące:

* Konfiguracja źródeł tożsamości
* Zarządzanie użytkownikami
* Usuwanie rozproszonej grupy portów
* Instalowanie rozwiązań vCenter (takich jak aplikacje do tworzenia kopii zapasowych)
* Tworzenie kont usług

> [!WARNING]
> Akcje podjęte w stanie eskalacji uprzywilejowanej mogą mieć negatywny wpływ na system i mogą spowodować, że system stanie się niedostępny. Wykonaj tylko niezbędne akcje w okresie eskalacji.

W portalu CloudSimple należy [eskalować uprawnienia](escalate-private-cloud-privileges.md) dla lokalnego użytkownika CloudOwner na serwerze vCenter SSO.  Możesz eskalować uprawnienia użytkownika zdalnego tylko wtedy, gdy na serwerze vCenter jest skonfigurowany dodatkowy dostawca tożsamości.  Eskalacja uprawnień obejmuje dodanie wybranego użytkownika do wbudowanej grupy administratorów vSphere.  Tylko jeden użytkownik może mieć uprawnienia eskalacji.  Jeśli musisz eskalować uprawnienia innego użytkownika, najpierw Anuluj eskalację uprawnień bieżących użytkowników.

Użytkownicy z dodatkowych źródeł tożsamości muszą zostać dodani jako członkowie grupy CloudOwner.

> [!CAUTION]
> Nowi użytkownicy muszą zostać dodani tylko *do chmury-właściciel-Grupa*, *chmura-Global-Cluster-admin-* Group, Cloud- *Global-Storage-Administrator-* Group, *Cloud-Global-Network-admin* -Group   Użytkownicy dodani do grupy *administratorzy* zostaną usunięci automatycznie.  Tylko konta usług należy dodać do grupy *administratorzy* , a konta usług nie mogą być używane do logowania się do interfejsu użytkownika sieci Web vSphere.

W okresie eskalacji CloudSimple korzysta ze zautomatyzowanego monitorowania ze skojarzonymi powiadomieniami o alertach, aby identyfikować wszelkie niezamierzone zmiany w środowisku.
