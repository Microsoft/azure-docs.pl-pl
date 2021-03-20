---
title: Ponowne uruchamianie Kreatora instalacji Azure AD Connect | Microsoft Docs
description: Wyjaśnia, w jaki sposób Kreator instalacji działa po raz drugi.
keywords: Kreator instalacji Azure AD Connect umożliwia skonfigurowanie ustawień konserwacji przy drugim uruchomieniu
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: d800214e-e591-4297-b9b5-d0b1581cc36a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d81836b47acb19f624075480aafef74c9c0934c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91306127"
---
# <a name="azure-ad-connect-sync-running-the-installation-wizard-a-second-time"></a>Azure AD Connect Sync: Uruchamianie Kreatora instalacji po raz drugi
Przy pierwszym uruchomieniu Kreatora instalacji Azure AD Connect przeprowadzi Cię przez proces konfigurowania instalacji. Po ponownym uruchomieniu Kreatora instalacji oferuje opcje konserwacji.

>[!IMPORTANT]
>Należy pamiętać, że nie można uruchomić Kreatora instalacji, gdy trwa synchronizacja.  Przed uruchomieniem kreatora upewnij się, że synchronizacja nie jest uruchomiona.

Kreatora instalacji można znaleźć w menu Start o nazwie **Azure AD Connect**.

![Menu Start](./media/how-to-connect-installation-wizard/startmenu.png)

Po uruchomieniu Kreatora instalacji zostanie wyświetlona strona z następującymi opcjami:

![Strona z listą dodatkowych zadań](./media/how-to-connect-installation-wizard/additionaltasks.png)

Jeśli zainstalowano usługi AD FS z Azure AD Connect, masz jeszcze więcej opcji. Dodatkowe opcje dla usług ADFS są udokumentowane w temacie [Zarządzanie usługami AD FS](how-to-connect-fed-management.md#manage-ad-fs).

Wybierz jedno z zadań, a następnie kliknij przycisk **dalej** , aby kontynuować.

> [!IMPORTANT]
> Gdy Kreator instalacji jest otwarty, wszystkie operacje w aparacie synchronizacji są zawieszane. Upewnij się, że Kreator instalacji został zamknięty zaraz po zakończeniu wprowadzania zmian w konfiguracji.
>
>

## <a name="view-current-configuration"></a>Wyświetl bieżącą konfigurację
Ta opcja umożliwia szybkie wyświetlenie aktualnie skonfigurowanych opcji.

![Strona z listą wszystkich opcji i ich Stanów](./media/how-to-connect-installation-wizard/viewconfig.png)

Kliknij przycisk **Wstecz** , aby wrócić. W przypadku wybrania opcji **Zakończ** Kreator instalacji zostanie zamknięty.

## <a name="customize-synchronization-options"></a>Dostosuj opcje synchronizacji
Ta opcja służy do wprowadzania zmian w konfiguracji synchronizacji. Zostanie wyświetlony podzestaw opcji ze ścieżki instalacji niestandardowej konfiguracji. Tę opcję można zobaczyć nawet wtedy, gdy najpierw użyto instalacji ekspresowej.

* [Dodaj więcej katalogów](how-to-connect-install-custom.md#connect-your-directories). Aby usunąć katalog, zobacz [usuwanie łącznika](how-to-connect-sync-service-manager-ui-connectors.md#delete).
* [Zmień filtrowanie domen i jednostek organizacyjnych](how-to-connect-install-custom.md#domain-and-ou-filtering).
* Usuń filtrowanie grup.
* [Zmień funkcje opcjonalne](how-to-connect-install-custom.md#optional-features).

Inne opcje instalacji początkowej nie mogą zostać zmienione i nie są dostępne. Dostępne są następujące opcje:

* Zmień atrybut, który ma być używany dla userPrincipalName i sourceAnchor.
* Zmień metodę sprzęgania dla obiektów z innego lasu.
* Włącz filtrowanie na podstawie grupy.

## <a name="refresh-directory-schema"></a>Odśwież schemat katalogu
Ta opcja jest używana, jeśli schemat został zmieniony w jednym z lokalnych lasów AD DS. Można na przykład zainstalować program Exchange lub uaktualnić go do schematu systemu Windows Server 2012 z obiektami urządzeń. W takim przypadku należy poinstruować Azure AD Connect, aby ponownie odczytać schemat z AD DS i zaktualizować jego pamięć podręczną. Ta akcja powoduje również ponowne wygenerowanie reguł synchronizacji. W przypadku dodania schematu programu Exchange na przykład reguły synchronizacji dla programu Exchange zostaną dodane do konfiguracji.

Po wybraniu tej opcji zostaną wyświetlone wszystkie katalogi w konfiguracji. Można zachować domyślne ustawienie i odświeżyć wszystkie lasy lub usunąć zaznaczenie niektórych z nich.

![Strona z listą wszystkich katalogów w środowisku](./media/how-to-connect-installation-wizard/refreshschema.png)

## <a name="configure-staging-mode"></a>Konfigurowanie trybu przejściowego
Ta opcja umożliwia włączenie i wyłączenie trybu przejściowego na serwerze. Więcej informacji na temat trybu przejściowego i sposobu jego użycia można znaleźć w temacie [operacje](how-to-connect-sync-staging-server.md).

Opcja wskazuje, czy przemieszczanie jest aktualnie włączone, czy wyłączone:  
![Zrzut ekranu pokazujący tryb przejściowy wyłączony.](./media/how-to-connect-installation-wizard/stagingmodecurrentstate.png)

Aby zmienić stan, zaznacz tę opcję i zaznacz lub usuń zaznaczenie pola wyboru.  
![Opcja, która również pokazuje bieżący stan trybu przemieszczania](./media/how-to-connect-installation-wizard/stagingmodeenable.png)

## <a name="change-user-sign-in"></a>Zmień Logowanie użytkownika
Ta opcja pozwala zmienić metodę logowania użytkownika na i z synchronizacji skrótów haseł, uwierzytelniania przekazywanego lub Federacji. Nie można zmienić na nie **Konfiguruj**.

Aby uzyskać więcej informacji na temat tej opcji, zobacz [Logowanie użytkownika](plan-connect-user-signin.md#changing-the-user-sign-in-method).

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o modelu konfiguracji używanym przez Azure AD Connect synchronizacji w temacie [zastrzeganie aprowizacji](concept-azure-ad-connect-sync-declarative-provisioning.md).

**Tematy dotyczące omówienia**

* [Synchronizacja programu Azure AD Connect: omówienie i dostosowywanie synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
