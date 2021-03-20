---
title: Reguły zapory na poziomie serwera
description: Reguły zapory na poziomie serwera
keywords: połączenie SQL, parametry połączenia
services: sql-database
author: dalechen
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.topic: include
ms.date: 07/13/2018
ms.author: ninarn
ms.openlocfilehash: 8d0f9899dbb7599340b8d15ca010a0157011fb9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67183310"
---
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

2. Na liście po lewej stronie wybierz pozycję **wszystkie usługi**.

3. Przewiń i wybierz pozycję **serwery SQL**.

    ![Znajdowanie serwera Azure SQL Database w portalu][b21-FindServerInPortal]
5. W polu tekstowym filtr Zacznij wpisywać nazwę serwera. Zostanie wyświetlony wiersz.

6. Wybierz wiersz dla serwera. Zostanie wyświetlony blok dla serwera.

7. W bloku serwer wybierz pozycję **Ustawienia**.

8. Wybierz opcję **Zapora**.

    ![Wybierz pozycję Ustawienia > Zapora][b31-SettingsFirewallNavig]
9. Wybierz pozycję **Dodaj adres IP klienta**. Wpisz nazwę nowej reguły w pierwszym polu tekstowym.

10. Wpisz wartości w polu niskie i wysokie adresy IP dla zakresu, który ma zostać włączony.

    * Może być użyteczna, aby zakończyć niską wartość z **0** i wysoką wartość na **. 255**.

11. Wybierz pozycję **Zapisz**.

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
