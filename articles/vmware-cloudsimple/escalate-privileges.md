---
title: Rozwiązanie VMware firmy Azure według CloudSimple — eskalacja uprawnień CloudSimple
description: Opisuje sposób eskalacji uprawnień CloudSimple do wykonywania funkcji administracyjnych w programie vCenter w chmurze prywatnej
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 36c6969ed89d0bb9222f52aa81de0d4128b9e533
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77025337"
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
> Nowi użytkownicy muszą zostać dodani tylko *do chmury-właściciel-Grupa*, *chmura-Global-Cluster-admin-* Group, Cloud- *Global-Storage-Administrator-* Group, *Cloud-Global-Network-admin* -Group *Cloud-Global-VM-Admin-Group*  Użytkownicy dodani do grupy *administratorzy* zostaną usunięci automatycznie.  Tylko konta usług należy dodać do grupy *administratorzy* , a konta usług nie mogą być używane do logowania się do interfejsu użytkownika sieci Web vSphere.

W okresie eskalacji CloudSimple korzysta ze zautomatyzowanego monitorowania ze skojarzonymi powiadomieniami o alertach, aby identyfikować wszelkie niezamierzone zmiany w środowisku.
